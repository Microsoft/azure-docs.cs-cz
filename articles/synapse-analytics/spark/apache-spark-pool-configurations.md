---
title: Koncepty Apache Spark fondů
description: Seznámení s Apache Spark velikostí a konfigurací fondů ve službě Azure synapse Analytics.
services: synapse-analytics
author: mlee3gsd
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 12/2/2020
ms.author: martinle
ms.reviewer: euang
ms.openlocfilehash: e68b8aff700519b6bef31e5126c91fa8bc4a3593
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2021
ms.locfileid: "103202512"
---
# <a name="apache-spark-pool-configurations-in-azure-synapse-analytics"></a>Konfigurace fondu Apache Spark ve službě Azure synapse Analytics

Fond Spark je sada metadat, které definují požadavky na výpočetní prostředky a související charakteristiky chování při vytváření instance instance Spark. Tyto vlastnosti zahrnují, ale nejsou omezené na název, počet uzlů, velikost uzlů, chování škálování a doba do provozu. Fond Spark sám o sobě nespotřebovává žádné prostředky. Při vytváření fondů Spark se neúčtují žádné náklady. Poplatky se účtují jenom v případě, že se v cílovém fondu Spark spustí úloha Sparku a instance Spark se vytvoří na vyžádání.

Můžete si přečíst, jak vytvořit fond Spark a zobrazit všechny jeho vlastnosti. Začněte [s fondy Spark v synapse Analytics](../quickstart-create-apache-spark-pool-portal.md) .

## <a name="nodes"></a>Uzly

Instance Apache Spark fondu se skládá z jednoho hlavního uzlu a dvou nebo více pracovních uzlů, které mají minimálně tři uzly v instanci Spark.  Hlavní uzel spouští další služby pro správu, například Livy, nitě Správce prostředků, Zookeeper a ovladač Spark.  Všechny uzly spouštějí služby, jako je například agent uzlu a správce uzlů příz. Všechny pracovní uzly spouštějí službu Spark prováděč.

## <a name="node-sizes"></a>Velikosti uzlů

Fond Spark se dá definovat s velikostmi uzlů, které jsou v rozsahu od malého výpočetního uzlu s 8 vCore a 64 GB paměti až do XXLarge výpočetního uzlu s 64 vCore a 432 GB paměti na uzel. Velikosti uzlů lze po vytvoření fondu změnit, i když je možné instanci restartovat.

|Velikost | Virtuální jádro | Memory (Paměť)|
|-----|------|-------|
|Malá|4|32 GB|
|Střední|8|64 GB|
|Velká|16|128 GB|
|XLarge|32|256 GB|
|XXLarge|64|432 GB|

## <a name="autoscale"></a>Automatické škálování

Fondy Apache Spark poskytují možnost automatického navýšení a snížení kapacity výpočetních prostředků na základě množství aktivity.  Když je povolená funkce automatického škálování, můžete nastavit minimální a maximální počet uzlů, které se mají škálovat.
Pokud je funkce automatického škálování zakázaná, počet nastavených uzlů zůstane pevný.  Toto nastavení lze po vytvoření fondu změnit, i když je možné instanci restartovat.

## <a name="automatic-pause"></a>Automatické pozastavení

Funkce automatického pozastavení uvolňuje prostředky po uplynutí nastavené doby nečinnosti, která snižuje celkové náklady na fond Apache Spark.  Počet minut času nečinnosti, po kterém je tato funkce povolená.  Funkce automatického pozastavení je nezávislá na funkci automatického škálování. Prostředky je možné pozastavit, pokud je automatické škálování povolené nebo zakázané.  Toto nastavení lze po vytvoření fondu změnit, i když je možné instanci restartovat.

## <a name="next-steps"></a>Další kroky

* [Azure Synapse Analytics](/azure/synapse-analytics)
* [Dokumentace k Apache Spark](https://spark.apache.org/docs/2.4.5/)
