---
title: zahrnout soubor
description: zahrnout soubor
services: automation
author: mgoedtel
ms.service: automation
ms.topic: include
ms.date: 06/29/2020
ms.author: magoedte
ms.custom: include file
ms.openlocfilehash: aa7b997fe112be5d974d0e82597e786fa219e1f5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85570134"
---
#### <a name="process-automation"></a>Automatizace procesů

| Prostředek | Omezení |Poznámky|
| --- | --- |---|
| Maximální počet nových úloh, které se dají odesílat každých 30 sekund na účet Azure Automation (neplánované úlohy) |100 |Po dosažení tohoto limitu selžou následné požadavky na vytvoření úlohy. Klient obdrží chybovou odpověď.|
| Maximální počet souběžně spuštěných úloh na stejné instanci času na účet služby Automation (neplánované úlohy) |200 |Po dosažení tohoto limitu selžou následné požadavky na vytvoření úlohy. Klient obdrží chybovou odpověď.|
| Maximální velikost úložiště metadat úlohy pro dobu provozu za 30 dní | 10 GB (přibližně 4 000 000 úloh)|Po dosažení tohoto limitu selžou následné požadavky na vytvoření úlohy. |
| Maximální limit počtu datových proudů úloh|1 MiB|Jeden datový proud nemůže být větší než 1 MB.|
| Maximální počet modulů, které se dají importovat každých 30 sekund na účet Automation |5 ||
| Maximální velikost modulu |100 MB ||
| Doba běhu úlohy, úroveň Free |500 minut na předplatné za kalendářní měsíc ||
| Maximální povolené množství místa na disku na izolovaný prostor<sup>1</sup> |1 GB |Platí jenom pro sandboxy Azure.|
| Maximální velikost paměti přidělené izolovanému prostoru<sup>1</sup> |400 MB |Platí jenom pro sandboxy Azure.|
| Maximální povolený počet síťových soketů na izolovaný prostor<sup>1</sup> |1 000 |Platí jenom pro sandboxy Azure.|
| Maximální povolený běh na Runbook<sup>1</sup> |3 hodiny |Platí jenom pro sandboxy Azure.|
| Maximální počet účtů Automation v předplatném |Bez omezení ||
| Maximální počet Hybrid Worker skupin na účet Automation|4 000||
|Maximální počet souběžných úloh, které lze spustit na jednom Hybrid Runbook Worker|50 ||
| Maximální velikost parametru úlohy Runbooku   | 512 kilobajtů||
| Maximální počet parametrů Runbooku   | 50|Pokud dosáhnete limitu 50 parametrů, můžete předat řetězci JSON nebo XML do parametru a analyzovat ho pomocí Runbooku.|
| Maximální velikost datové části Webhooku |  512 kilobajtů|
| Maximální počet dní, po které se uchovávají data úlohy|30 dní|
| Maximální velikost stavu pracovního postupu PowerShellu |5 MB| Platí pro Runbooky pracovního postupu PowerShellu při vytváření kontrolního bodu.|

<sup>1</sup> Izolovaný prostor (sandbox) je sdílené prostředí, které může používat víc úloh. Úlohy, které používají stejný izolovaný prostor (sandbox), jsou vázány omezeními prostředků izolovaného prostoru.

#### <a name="change-tracking-and-inventory"></a>Change Tracking a Inventory

V následující tabulce jsou uvedené limity sledovaných položek na počítač pro sledování změn.

| **Prostředek** | **Počtu**| **Poznámky** |
|---|---|---|
|Soubor|500||
|Registry|250||
|Software systému Windows|250|Neobsahuje aktualizace softwaru.|
|Balíčky Linux|1 250||
|Služby|250||
|Proces|250||

#### <a name="update-management"></a>Update Management

V následující tabulce jsou uvedena omezení pro Update Management.

| **Prostředek** | **Počtu**| **Poznámky** |
|---|---|---|
|Počet počítačů na nasazení aktualizace|1000||