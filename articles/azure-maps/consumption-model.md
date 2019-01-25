---
title: Model spotřeby v rámci služby Azure Maps | Microsoft Docs
description: Další informace o model spotřeby v rámci služby Azure Maps
author: subbarayudukamma
ms.author: skamma
ms.date: 05/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: cf20c7dbfbf7cd3f09579b03b835148c1c295137
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34600625"
---
# <a name="consumption-model"></a>Model spotřeby

Online směrování poskytuje podrobný popis Model spotřeby vehicle konkrétní sadu parametrů.
V závislosti na hodnotě **vehicleEngineType** jsou podporovány dva hlavní modely spotřeby: _Combustion_ a _Electric_. Zadání parametrů, které patří do různých modelů ve stejném požadavku, je chyba.
Model spotřeby nelze použít s **travelMode** hodnoty _jízdních kol_ a _chodců_.

## <a name="parameter-constraints-for-consumption-model"></a>Parametr omezení pro model spotřeby

V obou modelech spotřeba explicitně určit některé parametry vyžaduje zadání jiná také. Tyto závislosti jsou:

* Všechny parametry vyžadují **constantSpeedConsumption** být zadaný uživatel. Jedná se o chybu, chcete-li určit žádné jiné spotřeba modelu parametr, s výjimkou produktů **vehicleWeight**, pokud **constantSpeedConsumption*** není zadán.
* **accelerationEfficiency** a **decelerationEfficiency** je vždy třeba zadat jako párový údaj (tj. oba, nebo žádný).
* Pokud **accelerationEfficiency** a **decelerationEfficiency** nejsou zadány produktu jejich hodnoty nesmí být větší než 1 (aby se zabránilo perpetual pohybu).
* **uphillEfficiency** a **downhillEfficiency** je vždy třeba zadat jako párový údaj (tj. oba, nebo žádný).
* Pokud **uphillEfficiency** a **downhillEfficiency** nejsou zadány produktu jejich hodnoty nesmí být větší než 1 (aby se zabránilo perpetual pohybu).
* Pokud \* **efektivitu** parametry jsou zadané uživatelem, **vehicleWeight** musí být také zadána. Když **vehicleEngineType** je _spalovací_, **fuelEnergyDensityInMJoulesPerLiter** je třeba zadat také.
* **maxChargeInkWh** a **currentChargeInkWh** je vždy třeba zadat jako párový údaj (tj. oba, nebo žádný).

> [!NOTE]
> Pokud jenom **constantSpeedConsumption** je zadán, jsou žádné další aspekty spotřeba jako sklon a vehicle akcelerace vzít v úvahu pro výpočty spotřeby.

## <a name="combustion-consumption-model"></a>Model spotřeby spalovací

Model spotřeby combustion se používá, když je **vehicleEngineType** nastaveno na _combustion_.
Seznam parametrů, které patří k tomuto modelu jsou níže. Informace naleznete v sekci parametrů pro podrobný popis.

* constantSpeedConsumptionInLitersPerHundredkm
* VehicleWeight
* currentFuelInLiters
* auxiliaryPowerInLitersPerHour
* fuelEnergyDensityInMJoulesPerLiter
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="electric-consumption-model"></a>Model elektrické energie

Elektrické energie Model se používá při **vehicleEngineType** je nastaven na _electric_.
Seznam parametrů, které patří k tomuto modelu jsou níže. Informace naleznete v sekci parametrů pro podrobný popis.

* constantSpeedConsumptionInkWhPerHundredkm
* VehicleWeight
* currentChargeInkWh
* maxChargeInkWh
* auxiliaryPowerInkW
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="sensible-values-of-consumption-parameters"></a>Smysluplné hodnoty parametrů spotřeba

Konkrétní sadu parametrů spotřebě může být odmítnutá, i když ho může splnění explicitní požadavkům uvedeným výše. Ho se stane, když hodnota specifického parametru nebo kombinace hodnot několika parametrů, které se považuje za vést k nepřiměřený uskutečněných hodnoty spotřeby. Pokud k tomu dojde, s největší pravděpodobností znamená chybu vstupu jako správné je pozor na to pojmout všechny rozumný hodnoty parametrů spotřeby. V případě, že konkrétní sadu parametrů spotřeba byl odmítnut, v doprovodné chybové zprávě bude obsahovat textové vysvětlení z důvodů.
Podrobný popis parametry mají příklady rozumný hodnot pro oba modely.
