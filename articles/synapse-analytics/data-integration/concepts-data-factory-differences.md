---
title: Rozdíly oproti Azure Data Factory
description: Přečtěte si, jak se možnosti integrace dat služby Azure synapse Analytics liší od Azure Data Factory
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: be098977ba51e529aaecfb5dc3b7a03444f55a7e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91341891"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Integrace dat ve službě Azure synapse Analytics versus Azure Data Factory

Ve službě Azure synapse Analytics jsou možnosti integrace dat, jako jsou kanály synapse a toky dat, založené na Azure Data Factory. Další informace najdete v tématu [co je Azure Data Factory](../../data-factory/introduction.md). Téměř všechny funkce jsou stejné nebo podobné a dokumentace je sdílená mezi těmito dvěma službami. V tomto článku se zvýrazňují a identifikují se aktuální rozdíly mezi Azure Data Factory a Azure synapse.

Pokud chcete zjistit, jestli se funkce Azure Data Factory nebo článek vztahuje na Azure synapse, zkontrolujte moniker v horní části článku.

![Platí pro moniker](../media/concepts-data-factory-differences/applies-to-moniker.png "Platí pro moniker")

## <a name="features-in-azure-data-factory-not-planned-for-azure-synapse"></a>Funkce v Azure Data Factory nejsou plánované pro Azure synapse

V Azure Data Factory jsou k dispozici následující funkce, ale nejsou plánovány pro Azure synapse.

* Možnost naSSIS balíčky a přesunout je na ně.
* Snowflake jako jímku v aktivitě kopírování a toku dat mapování.
* Nastavení času toku dat při namapování na živé prostředí Azure Integration runtime.

## <a name="azure-synapse-features-not-supported-in-azure-data-factory"></a>Funkce Azure synapse nejsou podporované v Azure Data Factory

V Azure synapse jsou k dispozici následující funkce, ale nejsou plánovány pro Azure Data Factory.

* Monitorování úloh Sparku toků mapování dat je k dispozici pouze v synapse. V synapse je modul Spark obsažený v předplatném uživatele, aby si uživatelé mohli zobrazit podrobné protokoly Sparku. V Azure Data Factory dojde k provádění úloh na clusteru Spark spravovaném Azure Data Factory. 

## <a name="azure-data-factory-features-that-behave-differently-in-synapse"></a>Azure Data Factory funkce, které se chovají jinak v synapse

Následující funkce se chovají odlišně nebo v současnosti neexistují v Azure synapse. 

* Toky dat tahání
* Galerie šablon řešení
* Integrace Gitu a nativní řešení CI/CD
* Integrace se službou Azure monitor
* Přejmenování prostředků po publikování
* Konfigurace hybridního prostředí Integration runtime v pracovním prostoru synapse Uživatel nemůže mít spravovanou virtuální síť IR i Azure IR.
* Sdílení modulu runtime integrace mezi synapse pracovními prostory

## <a name="next-steps"></a>Další kroky

Začněte s integrací dat v pracovním prostoru synapse a Naučte se, jak ingestovat [data do účtu Azure Data Lake Storage Gen2](data-integration-data-lake.md).
