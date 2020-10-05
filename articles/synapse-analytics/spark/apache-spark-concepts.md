---
title: Apache Spark základní koncepty
description: Seznámení s Apache Spark ve službě Azure synapse Analytics a v různých konceptech.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 74e85906742207d6cde0b7c4cc5c021c23ee4c7b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91260134"
---
# <a name="apache-spark-in-azure-synapse-analytics-core-concepts"></a>Apache Spark v základních konceptech služby Azure synapse Analytics

Apache Spark je paralelní procesor pro zpracování, který podporuje zpracování v paměti, aby se zvýšil výkon analytických aplikací s velkým objemem dat. Apache Spark ve službě Azure synapse Analytics je jedním z implementací Apache Spark v cloudu od Microsoftu. 

Azure synapse usnadňuje vytváření a konfiguraci možností Sparku v Azure. Azure synapse poskytuje odlišnou implementaci těchto funkcí Sparku, které jsou popsány zde.

## <a name="spark-pools-preview"></a>Fondy Sparku (Preview)

Ve Azure Portal se vytvoří fond Spark (Preview). Je to definice fondu Spark, který při vytvoření instance slouží k vytvoření instance Sparku, která zpracovává data. Když se vytvoří fond Sparku, existuje jenom jako metadata a žádné prostředky se nespotřebovává, neúčtují ani neúčtují. Fond Spark má řadu vlastností, které řídí charakteristiky instance Spark. Tyto vlastnosti zahrnují, ale nejsou omezené na název, velikost, chování škálování, doba do živého.

Protože se k vytváření fondů Sparku nevztahují žádné dolary nebo náklady na prostředky, je možné jakékoli číslo vytvořit s libovolným počtem různých konfigurací. Oprávnění lze také použít na fondy Spark, které uživatelům umožňují mít přístup pouze k některým a jiným uživatelům.

Osvědčeným postupem je vytvořit menší fondy Sparku, které se dají použít pro vývoj a ladění a pak větší pro spouštění produkčních úloh.

Můžete si přečíst, jak vytvořit fond Spark a zobrazit všechny jeho vlastnosti. Začněte [s fondy Spark v synapse Analytics](../quickstart-create-apache-spark-pool-portal.md) .

## <a name="spark-instances"></a>Instance Spark

Instance Spark se vytvoří, když se připojíte ke fondu Spark, vytvoříte relaci a spustíte úlohu. Protože k jednomu fondu Spark může mít přístup více uživatelů, vytvoří se nová instance Spark pro každého uživatele, který se připojí. 

Když odešlete druhou úlohu, pokud ve fondu existuje kapacita, má stávající instance Spark také kapacitu. Pak existující instance zpracuje úlohu. V opačném případě, pokud je kapacita dostupná na úrovni fondu, vytvoří se nová instance Spark.

## <a name="examples"></a>Příklady

### <a name="example-1"></a>Příklad 1

- Vytvoříte fond Spark s názvem SP1; má pevnou velikost clusteru 20 uzlů.
- Odešlete úlohu poznámkového bloku J1, která používá 10 uzlů, vytvoří se instance Spark SI1 pro zpracování úlohy.
- Nyní odešlete další úlohu J2, která používá 10 uzlů, protože stále existuje kapacita fondu a instance, J2 se zpracovává pomocí SI1.
- Pokud J2 požádal na 11 uzlů, v SP1 nebo SI1 by se nedostala kapacita. V takovém případě, pokud J2 přichází z poznámkového bloku, úloha se odmítne. Pokud J2 přichází z úlohy služby Batch, bude zařazena do fronty.

### <a name="example-2"></a>Příklad 2

- Vytvoříte fond Spark volání SP2; má povolené automatické škálování 10 – 20 uzlů.
- Odešlete úlohu poznámkového bloku J1, která používá 10 uzlů, vytvoří se instance Spark SI1 a zpracuje úlohu.
- Nyní odešlete další úlohu J2, která používá 10 uzlů, protože ve fondu stále existuje kapacita, kterou instance automaticky roste na 20 uzlů a procesy J2.

### <a name="example-3"></a>Příklad 3

- Vytvoříte fond Spark s názvem SP1; má pevnou velikost clusteru 20 uzlů.
- Odešlete úlohu poznámkového bloku J1, která používá 10 uzlů, vytvoří se instance Spark SI1 pro zpracování úlohy.
- Jiný uživatel, U2, odešle úlohu, J3, která používá 10 uzlů, vytvoří se nová instance Spark SI2, která zpracuje úlohu.
- Nyní odešlete další úlohu J2, která používá 10 uzlů, protože stále existují kapacity ve fondu a instance J2 je zpracována SI1.

## <a name="next-steps"></a>Další kroky

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Dokumentace k Apache Spark](https://spark.apache.org/docs/2.4.4/)
