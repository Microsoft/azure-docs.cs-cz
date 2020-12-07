---
title: Vysoká dostupnost a zotavení po havárii
titleSuffix: Azure Digital Twins
description: Popisuje funkce digitálních vláken Azure a Azure, které vám pomůžou vytvořit vysoce dostupná řešení Azure IoT s možnostmi zotavení po havárii.
author: baanders
ms.author: baanders
ms.date: 10/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: ac75a5b0b59a06855b7ee88d971c269ca915e429
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2020
ms.locfileid: "96763159"
---
# <a name="azure-digital-twins-high-availability-and-disaster-recovery"></a>Vysoká dostupnost a zotavení po havárii pro digitální vlákna Azure

Klíčovou oblastí, kterou je třeba zvážit pro odolná řešení IoT, je provozní kontinuita a zotavení po havárii. Návrh na **vysokou dostupnost (ha)** a **zotavení po havárii (Dr)** vám může pomáhat definovat a dosahovat odpovídajících cílů v době provozu pro vaše řešení.

Tento článek popisuje funkce HA a zotavení po havárii, které nabízí konkrétně služba Azure Digital revláken.

Funkce digitálních vláken Azure podporuje tyto možnosti:
* *Vysoce v oblasti ha* – integrovaná redundance, která doručí dobu provozu služby
* Různá *oblast Dr* – převzetí služeb při selhání v geograficky spárované oblasti Azure v případě neočekávaného selhání datového centra

V části věnované [*osvědčeným postupům*](#best-practices) si můžete prohlédnout obecné pokyny k Azure pro navrhování pro ha/Dr.

## <a name="intra-region-ha"></a>HA uvnitř oblasti
 
Digitální vlákna Azure zajišťuje vysoké dostupnosti v rámci oblasti, protože implementuje redundance v rámci služby. To se projeví v [smlouvě SLA](https://azure.microsoft.com/support/legal/sla/digital-twins) pro dobu provozu. **Vývojáři řešení digitálních vláken Azure nevyžadují žádnou další práci, aby mohli využívat tyto funkce HA.** I když digitální vlákna Azure nabízí dostatečně vysokou zaručenou dobu provozu, může se u všech distribuovaných výpočetních platforem stále očekávat přechodné selhání. Příslušné zásady opakování by měly být integrované pro součásti, které pracují s cloudovou aplikací pro řešení přechodných chyb.

## <a name="cross-region-dr"></a>Vzájemná oblast DR

Může dojít k několika vzácným situacím, když se v datovém centru rozšíří výpadky z důvodu výpadků napájení nebo jiných událostí v oblasti. Tyto události jsou vzácné a v takových selháních nemusí funkce vysoké oblasti HA popsaná výše pomohly. U digitálních vláken Azure se tato adresa řeší při převzetí služeb při selhání iniciované společností Microsoft.

**Převzetí služeb při selhání iniciované společností Microsoft** je ve výjimečných situacích vykonáváno při převzetí služeb při selhání všemi instancemi digitálních vláken Azure z ovlivněné oblasti do odpovídající geografické oblasti. Tento proces je výchozí možností (bez způsobu, jak uživatelům odhlásit) a nevyžaduje od uživatele zásah. Společnost Microsoft si vyhrazuje právo stanovit, kdy bude tato možnost uplatněna. Tento mechanismus nezahrnuje souhlas uživatele před převzetím služeb při selhání instance uživatele.

>[!NOTE]
> Některé služby Azure také poskytují další možnost s názvem **převzetí služeb při selhání iniciované zákazníkem**, které zákazníkům umožňuje iniciovat převzetí služeb při selhání jenom pro jejich instance, jako je třeba spuštění postupu zotavení po havárii. Tento mechanismus v současné době **nepodporují** digitální vlákna Azure. 

## <a name="best-practices"></a>Osvědčené postupy

Osvědčené postupy týkající se HA/zotavení po havárii najdete v následujících pokynech k Azure v tomto tématu: 
* V článku [*technické postupy pro provozní kontinuitu Azure*](/azure/architecture/framework/resiliency/overview) se popisuje obecné rozhraní, které vám pomůžou zvážit kontinuitu podnikových aplikací a zotavení po havárii. 
* Dokument pro [*zotavení po havárii a vysokou dostupnost pro aplikace Azure*](/azure/architecture/framework/resiliency/backup-and-recovery) poskytuje pokyny pro architekturu pro aplikace Azure, které umožňují dosáhnout vysoké dostupnosti (ha) a zotavení po havárii (Dr).

## <a name="next-steps"></a>Další kroky 

Přečtěte si další informace o tom, jak začít s řešeními digitálních vláken Azure:
 
* [*Co je služba Azure Digital Twins?*](overview.md)
* [*Rychlý Start: Prozkoumejte vzorový scénář*](quickstart-adt-explorer.md)