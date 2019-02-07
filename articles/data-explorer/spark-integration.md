---
title: Konektor Azure Průzkumník dat pro Apache Spark
description: Tento článek ukazuje, jak použít Průzkumník dat Azure konektor pro Apache Spark.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 02/05/2018
ms.openlocfilehash: 6f115df97d0b790c94d6dc07f3f40feeceb1a7cc
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824049"
---
# <a name="azure-data-explorer-connector-for-apache-spark"></a>Konektor Azure Průzkumník dat pro Apache Spark

Pomocí Průzkumníku dat Azure a Apache Sparku, mohou vytvářet rychlé a škálovatelné aplikace využívající scénáře, jako je machine learning (ML), extrakce, transformace a načítání (ETL) a Log Analytics na základě dat.

## <a name="prerequisites"></a>Požadavky

K používání konektoru, musí mít vaše aplikace:

* Java 1.8 SDK
* Maven 3.x
* Spark verze 2.3.2 nebo novější

## <a name="link-to-data-explorer"></a>Odkaz do Průzkumníku dat

Pro aplikace Scala a Java pomocí nástroje Maven projektu definice, propojení vaší aplikace pomocí následujícího artefaktu:

```java
groupId = com.microsoft.azure
artifactId = spark-kusto-connector
version = 1.0.0-Beta-01 
```

V nástroji Maven propojte s následujícími možnostmi:

```Maven
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spark-kusto-connector</artifactId>
    <version>1.0.0-Beta-01</version>
  </dependency>
```

## <a name="build-commands"></a>Příkazy sestavení

Na soubor jar sestavit a spustit všechny testy:

```
mvn clean package
```

Pokud chcete vytvořit soubor jar, spustit všechny testy a nainstalujte soubor jar do místního úložiště Maven:

```
mvn clean install
```