---
title: Vysvětlení výpočetní jednotky v Azure databáze pro databázi MySQL | Microsoft Docs
description: 'Azure DB pro databázi MySQL: Tento článek vysvětluje koncepty výpočetní jednotky a co se stane, když vaše úlohy dosáhne maximální výpočetní jednotky.'
services: mysql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 09/29/2017
ms.openlocfilehash: 2c4894ae9a4235f9ced4a8d9b991238543646f53
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2018
ms.locfileid: "29678445"
---
# <a name="explaining-compute-units-in-azure-database-for-mysql"></a>Vysvětlení výpočetní jednotky v Azure databáze pro databázi MySQL
Toto téma vysvětluje Princip výpočetní jednotky a co se stane, když vaše úlohy dosáhne maximální výpočetní jednotky.

## <a name="what-are-compute-units"></a>Co jsou výpočetní jednotky?
Výpočetní že jednotky jsou měřítkem propustnost zpracování procesoru, který zaručeně být k dispozici pro jednu databázi Azure pro server databáze MySQL. Výpočetní jednotka je kombinaci měření prostředků procesoru a paměti. 50 výpočetní jednotky obecně rovnat polovinu jádro. 100 výpočetní jednotky rovnat jednoho jádra. 2 000 výpočetní jednotky rovnat 20 jader propustnosti garantované zpracování k dispozici pro váš server.

Množství paměti na výpočetní jednotku je optimalizovaná pro Basic a Standard cenové úrovně. Zvýší výpočetní jednotky zvýšením úrovně výkonu rovná zvýší sadu prostředků, které jsou k dispozici pro jednu databázi Azure pro databázi MySQL.

Standardní účet 800 výpočetní jednotky například poskytuje propustnosti 8 x další procesoru a paměti, než standardní 100 výpočetní jednotky konfigurace. Zatímco standardní 100 výpočetní jednotky poskytují stejné ve srovnání s základní 100 výpočetní jednotky propustnosti procesoru, je množství paměti, který je předem nakonfigurované v standardní cenovou úroveň však double objem paměti konfigurované pro základní cenovou úroveň. Proto cenová úroveň Standard poskytuje lepší výkon pracovního vytížení a nižší latenci transakcí než základní cenové úrovně se stejným výpočetní jednotky vybrané.

## <a name="how-can-i-determine-the-number-of-compute-units-needed-for-my-workload"></a>Jak můžete určit počet výpočetní jednotky potřebné pro moje zatížení?
Pokud chcete migrovat existující server MySQL běžících místně nebo na virtuálním počítači, můžete určit počet výpočetní jednotky pomocí odhad, kolik jader propustnosti zpracování vaše úlohy vyžadují. 

Pokud vaše stávající místní nebo virtuální počítač server aktuálně používá 4 jádra (bez počítání hyperthread procesoru), spusťte nakonfigurováním 400 výpočetní jednotky pro vaši databázi Azure pro server databáze MySQL. Výpočetní jednotky můžete škálovat nahoru nebo dolů dynamicky podle potřeb pracovního vytížení a se skoro žádné výpadky aplikací. 

Monitorování grafu metriky na portálu Azure nebo psát příkazy rozhraní příkazového řádku Azure k měření výpočetní jednotky. Relevantní metriky pro monitorování jsou procento výpočetní jednotky a výpočetní jednotky limit.

>[!IMPORTANT]
> Pokud zjistíte, úložiště, které nejsou IOPS plně využívat na maximum, vezměte v úvahu monitorování na výpočetní jednotky využití. Vyvolání výpočetní jednotky může povolit pro vyšší propustnost vstupně-výstupní operace lessening kritická místa výkonu vyplývající z omezené procesoru nebo paměti.

## <a name="what-happens-when-i-hit-my-maximum-compute-units"></a>Co se stane, když dosáhnu Moje maximální výpočetní jednotky?
Úrovně výkonu jsou kalibrován a řídí k poskytnutí prostředků ke spuštění úlohy databáze až do maximální limity pro vybrané cenové úrovně a výkonu. 

Pokud vaše úlohy dosáhne maximální limit v buď výpočetní jednotky nebo zřízené IOPS omezení, můžete nadále používat prostředky na maximální povolenou úroveň, ale vaše dotazy nejsou setkat vyšší latence. Tyto limity za následek zpomalení zatížení místo chyby, pokud zpomalení stane závažné, který dotazuje vypršení časového limitu. 

Pokud vaše úlohy dosáhne maximální limit počtu připojení, je vyvolána explicitní chyby. Další informace o omezení prostředků najdete v tématu [omezení v Azure Database pro databázi MySQL](concepts-limits.md).

## <a name="next-steps"></a>Další postup
Další informace o cenových úrovní najdete v tématu [Azure Database pro databázi MySQL cenové úrovně](./concepts-service-tiers.md).
