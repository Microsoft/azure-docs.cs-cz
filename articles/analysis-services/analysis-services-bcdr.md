---
title: Azure Analysis Services vysoká dostupnost | Microsoft Docs
description: Tento článek popisuje, jak Azure Analysis Services poskytuje vysokou dostupnost během přerušení služby.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: abfcc38601887cd14509ac436e0344b681e3577e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86506747"
---
# <a name="analysis-services-high-availability"></a>Vysoká dostupnost služby Analysis Services

Tento článek popisuje, aby byla zajištěna vysoká dostupnost Azure Analysis Servicesch serverů. 

## <a name="assuring-high-availability-during-a-service-disruption"></a>Zajištění vysoké dostupnosti při přerušení služby

V některých případech může být datové centrum Azure výpadkem. Když dojde k výpadku, dojde k narušení chodu firmy, které může trvat několik minut nebo trvat po hodinách. K zajištění vysoké dostupnosti se nejčastěji dosahuje redundance serveru. Pomocí Azure Analysis Services můžete dosáhnout redundance vytvořením dalších sekundárních serverů v jedné nebo několika oblastech. Při vytváření redundantních serverů, aby se zajistilo, že data a metadata na těchto serverech se synchronizují se serverem v oblasti, která přešla do režimu offline, můžete:

* Nasaďte modely na redundantní servery v jiných oblastech. Tato metoda vyžaduje zpracování dat na primárním serveru i redundantních serverech paralelně. zajišťuje, že všechny servery jsou synchronizované.

* [Zálohujte](analysis-services-backup.md) databáze z primárního serveru a obnovte je na redundantních serverech. Například můžete automatizovat Noční zálohování do služby Azure Storage a obnovit je na jiné redundantní servery v jiných oblastech. 

V obou případech, pokud dojde k výpadku primárního serveru, je nutné změnit připojovací řetězce v klientech vytváření sestav, aby se připojovaly k serveru v jiném regionálním datovém centru. Tato změna by se měla považovat za poslední a jenom v případě, že dojde k závažnému výpadku místního datového centra. Je pravděpodobnější, že výpadek datového centra hostujícího váš primární server se vrátí zpět do online režimu předtím, než bude možné aktualizovat připojení na všech klientech. 

Abyste se vyhnuli nutnosti měnit připojovací řetězce u klientů pro vytváření sestav, můžete vytvořit [alias](analysis-services-server-alias.md) serveru pro svůj primární server. Pokud dojde k výpadku primárního serveru, můžete změnit alias tak, aby odkazoval na redundantní Server v jiné oblasti. Alias můžete automatizovat na název serveru tak, že na primárním serveru zakódujete kontrolu stavu koncového bodu. Pokud se ověření stavu nepovede, může stejný koncový bod směrovat na redundantní Server v jiné oblasti. 

## <a name="related-information"></a>Související informace

[Zálohování a obnovení](analysis-services-backup.md)   
[Správa Azure Analysis Services](analysis-services-manage.md)   
[Názvy serverů s aliasem](analysis-services-server-alias.md) 

