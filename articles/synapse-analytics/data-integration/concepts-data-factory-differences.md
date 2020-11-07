---
title: Rozdíly oproti Azure Data Factory
description: Přečtěte si, jak se možnosti integrace dat služby Azure synapse Analytics liší od Azure Data Factory
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/06/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: 10f5336dd4c8a02acf623b1b14226ca676006953
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357645"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Integrace dat ve službě Azure synapse Analytics versus Azure Data Factory

Ve službě Azure synapse Analytics jsou možnosti integrace dat, jako jsou kanály synapse a toky dat, založené na Azure Data Factory. Další informace najdete v tématu [co je Azure Data Factory](../../data-factory/introduction.md). Téměř všechny funkce jsou stejné nebo podobné a dokumentace je sdílená mezi těmito dvěma službami. V tomto článku se zvýrazňují a identifikují se aktuální rozdíly mezi Azure Data Factory a Azure synapse.

Pokud chcete zjistit, jestli se funkce Azure Data Factory nebo článek vztahuje na Azure synapse, zkontrolujte moniker v horní části článku.

![Platí pro moniker](../media/concepts-data-factory-differences/applies-to-moniker.png "Platí pro moniker")

## <a name="features-in-azure-data-factory-not-planned-for-azure-synapse"></a>Funkce v Azure Data Factory nejsou plánované pro Azure synapse

V Azure Data Factory jsou k dispozici následující funkce, ale nejsou plánovány pro Azure synapse.

* **SSIS balíčky pro životní cyklus a posunutí:** V Azure Data Factory máte možnost naSSIS balíčky a přesouvat je pomocí prostředí SSIS Integration runtime. V pracovních prostorech synapse nejsou k dispozici aktivity SSIS Integration runtime i SSIS pro spuštění balíčku. 
* **Doba do provozu:** Hodnota TTL (Time to Live) je nastavení v prostředí Azure Integration runtime, které umožňuje clusteru Spark v mapování datových toků, *aby zůstaly* zaplněny po určitou dobu po dokončení toku dat. Tato funkce není k dispozici v pracovních prostorech synapse.

## <a name="azure-synapse-features-not-supported-in-azure-data-factory"></a>Funkce Azure synapse nejsou podporované v Azure Data Factory

V Azure synapse jsou k dispozici následující funkce, ale nejsou plánovány pro Azure Data Factory.

* **Monitorování úloh Sparku pro mapování toků dat:** V synapse je modul Spark obsažený v předplatném uživatele, aby si uživatelé mohli zobrazit podrobné protokoly Sparku. V Azure Data Factory dojde k provádění úloh v clusteru Spark Azure Data Factory spravovaném a tyto informace nejsou k dispozici. 

## <a name="azure-data-factory-features-that-behave-differently-in-synapse"></a>Azure Data Factory funkce, které se chovají jinak v synapse

Následující funkce se chovají odlišně nebo v současnosti neexistují v Azure synapse. 

* **Toky dat tahání:** Aktivita toku dat tahání je v tuto chvíli dostupná jenom v Azure Data Factory.
* **Galerie šablon řešení:** V Azure Data Factory můžou uživatelé najít šablony kanálu v galerii šablon řešení. V pracovních prostorech synapse obsahuje znalostní centrum jinou sadu šablon spolu s dalšími datovými sadami a skripty SQL. 
* **Integrace Gitu a nativní řešení CI/CD:** V současné době se pracovní prostor synapse nemůže připojit k úložišti Git ani se neřídí stejným procesem nepřetržité integrace a doručování jako Azure Data Factory.
* **Integrace se službou Azure monitor:** Pracovní prostory synapse se neintegrují s Azure Monitor jako Azure Data Factory.
* **Konfigurace hybridního prostředí Integration Runtime:** V pracovním prostoru synapse nemůže uživatel mít spravovanou virtuální síť IR i Azure IR. Tato funkce je podporovaná v Azure Data Factory.
* **Sdílení modulu runtime integrace:** Místní prostředí Integration runtime se nedají sdílet mezi synapse pracovními prostory. Tato funkce je podporovaná v Azure Data Factory.
* **Prostředí Integration runtime pro různé oblasti pro toky dat:** Datové toky nemůžou běžet v prostředí Integration runtime v různých oblastech než synapse pracovní prostor. Tato funkce je podporovaná v Azure Data Factory.

## <a name="next-steps"></a>Další kroky

Začněte s integrací dat v pracovním prostoru synapse a Naučte se, jak ingestovat [data do účtu Azure Data Lake Storage Gen2](data-integration-data-lake.md).
