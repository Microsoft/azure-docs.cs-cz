---
title: Přehled toku dat Azure Data Factory mapování
description: Přehledné vysvětlení mapování toků dat v Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 6f4c124c59584c8538d85ac61650661ae559a77b
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70123959"
---
# <a name="what-are-mapping-data-flows"></a>Co jsou mapování toků dat?

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Mapování datových toků je vizuálně navržené transformace dat v Azure Data Factory. Datové toky umožňují technikům pro transformaci dat pracovat s grafickými logikami bez psaní kódu. Výsledné toky dat se spouštějí jako aktivity v Azure Data Factory kanálech pomocí Azure Databricksch clusterů s horizontálním navýšení kapacity.

Cílem Azure Data Factoryho toku dat je poskytnout plně vizuální prostředí bez nutnosti kódování. Vaše toky dat budou spouštěny ve vlastním clusteru spouštění pro zpracování dat se škálováním na více instancí. Azure Data Factory zpracovává všechny překlady kódu, optimalizaci cest a provádění úloh toku dat.

Začněte vytvořením toků dat v režimu ladění, abyste mohli logiku transformace interaktivně ověřit. Potom do svého kanálu přidejte aktivitu toku dat, která spustí a otestuje tok dat v ladění kanálu, nebo pomocí triggeru Now (aktivovat) v kanálu otestujte tok dat z aktivity kanálu.

Vaše aktivity toku dat pak naplánujete a budete monitorovat pomocí Azure Data Factory kanálů, které spouštějí aktivitu toku dat.

Přepínač režimu ladění na návrhové ploše toku dat umožňuje interaktivní tvorbu transformací dat. Režim ladění poskytuje prostředí pro přípravu dat a náhled dat pro konstrukci toku dat.

## <a name="begin-building-your-data-flow-logical-graph"></a>Začněte sestavovat logický Graf toku dat

Začněte sestavovat toky dat pomocí znaménka + v části prostředky továrny a vytvořte nový tok dat.

![Nový tok dat](media/data-flow/newdataflow2.png "Nový tok dat")

Začněte konfigurací zdrojové transformace a potom do každého následného kroku přidejte transformaci dat pomocí znaménka +. Při sestavování logického grafu můžete přepínat mezi režimy grafu a konfigurace pomocí tlačítka "Zobrazit graf" a "Skrýt graf".

![Zobrazit graf](media/data-flow/showg.png "Zobrazit graf")

## <a name="configure-transformation-logic"></a>Konfigurovat logiku transformace

![Skrýt graf](media/data-flow/hideg.png "Skrýt graf")

Skrytím grafu budete moci později procházet uzly pro transformaci.

![Přejít] na (media/data-flow/showhide.png "Přejít") na

## <a name="next-steps"></a>Další kroky

* [Začátek se zdrojovou transformací](data-flow-source.md)
