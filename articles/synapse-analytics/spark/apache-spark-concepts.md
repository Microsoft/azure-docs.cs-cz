---
title: Apache Spark v Azure Synapse Analytics – základní koncepty
description: Tento článek obsahuje úvod do Apache Spark v Azure Synapse Analytics a různé koncepty.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 3cf654e77bf68c5194a0213d4452242b5c44e234
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423667"
---
# <a name="apache-spark-in-azure-synapse-analytics-core-concepts"></a>Apache Spark v základních konceptech Azure Synapse Analytics

Apache Spark je paralelní architektura zpracování, která podporuje zpracování v paměti pro zvýšení výkonu analytických aplikací pro velká data. Apache Spark v Azure Synapse Analytics je jednou z implementací Apache Spark společnosti Microsoft v cloudu. 

Azure Synapse usnadňuje vytváření a konfiguraci funkcí Spark v Azure. Azure Synapse poskytuje různé implementace těchto funkcí Spark, které jsou zde popsány.

## <a name="spark-pools-preview"></a>Zapalovací fondy (náhled)

Fond Spark (preview) se vytvoří na webu Azure Portal. Je to definice fondu Spark, který při vytváření instancí se používá k vytvoření instance Spark, která zpracovává data. Když je vytvořen fond Spark, existuje pouze jako metadata; žádné prostředky jsou spotřebovány, spuštěny nebo účtovány. Fond Spark má řadu vlastností, které řídí charakteristiky instance Spark; Tyto vlastnosti zahrnují, ale nejsou omezeny na název, velikost, škálování chování, čas žít.

Vzhledem k tomu, že s vytvářením fondů Spark nejsou spojeny žádné náklady na dolar nebo prostředky, lze vytvořit libovolné číslo s libovolným počtem různých konfigurací. Oprávnění lze použít také pro fondy Sparku, které uživatelům umožňují přístup pouze k některým a nikoli k jiným.

Osvědčeným postupem je vytvořit menší fondy Spark, které mohou být použity pro vývoj a ladění a pak větší pro spuštění produkčních úloh.

Můžete si přečíst, jak vytvořit fond Spark a vidět všechny jejich vlastnosti zde [Začínáme s fondy Spark v Synapse Analytics](apache-spark-notebook-create-spark-use-sql.md#create-an-apache-spark-pool)

## <a name="spark-instances"></a>Instance jiskry

Instance Spark se vytvoří, když se připojíte k fondu Spark, vytvoříte relaci a spustíte úlohu. Vzhledem k tomu, že k jednomu fondu Spark může mít přístup více uživatelů, vytvoří se pro každého uživatele, který se připojí, nová instance Spark. 

Když odešlete druhou úlohu, pak pokud je kapacita ve fondu, existující instance Spark má také kapacitu, pak existující instance zpracuje úlohu; pokud ne a je kapacita na úrovni fondu, pak bude vytvořena nová instance Spark.

## <a name="examples"></a>Příklady

### <a name="example-1"></a>Příklad 1

- Vytvoříte fond Spark s názvem SP1; má pevnou velikost clusteru 20 uzlů.
- Odešlete úlohu poznámkového bloku, J1, která používá 10 uzlů, instanci Spark, si1 je vytvořen pro zpracování úlohy.
- Nyní odešlete jinou úlohu, J2, která používá 10 uzlů, protože ve fondu je stále kapacita a instance J2 je zpracována si1.
- Pokud by Společnost J2 požádala o 11 uzlů, nebyla by kapacita v sp1 nebo SI1. V tomto případě, pokud J2 pochází z notebooku, pak úloha bude odmítnuta; Pokud J2 pochází z dávkové úlohy, bude zařazendo fronty.

### <a name="example-2"></a>Příklad 2

- Vytvoření volání fondu Spark SP2; má automatické škálování povoleno 10 - 20 uzlů
- Odešlete úlohu poznámkového bloku, J1, která používá 10 uzlů, instance Spark, SI1, je vytvořena ke zpracování úlohy.
- Nyní odešlete jinou úlohu, J2, která používá 10 uzlů, protože ve fondu je stále kapacita, instance automaticky roste na 20 uzlů a zpracovává J2.

### <a name="example-3"></a>Příklad 3

- Vytvoříte fond Spark s názvem SP1; má pevnou velikost clusteru 20 uzlů.
- Odešlete úlohu poznámkového bloku, J1, která používá 10 uzlů, instanci Spark, si1 je vytvořen pro zpracování úlohy.
- Jiný uživatel, U2, odešle úlohu, J3, která používá 10 uzlů, nová instance Spark, SI2, je vytvořena ke zpracování úlohy.
- Nyní odešlete jinou úlohu, J2, která používá 10 uzlů, protože ve fondu je stále kapacita a instance J2 je zpracována si1.

## <a name="next-steps"></a>Další kroky

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Dokumentace Apache Spark](https://spark.apache.org/docs/2.4.4/)
