---
title: Model spotřeby v rámci služby Azure Maps | Microsoft Docs
description: Další informace o model spotřeby v rámci služby Azure Maps
services: azure-maps
keywords: ''
author: subbarayudukamma
ms.author: skamma
ms.date: 5/8/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: ''
ms.openlocfilehash: 146ea084c02bb3de0c74da79ca85021589207de8
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="consumption-model"></a>Model spotřeby

Online směrování poskytuje podrobný popis Model spotřeby vehicle konkrétní sadu parametrů.
V závislosti na hodnotě **vehicleEngineType**, jsou podporovány dva hlavní spotřeba modely: _spalovací_ a _Electric_. Zadání parametrů, které patří do různých modelů ve stejném požadavku se stala chyba.
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

Se používá Model spotřeby spalovací při **vehicleEngineType** je nastaven na _spalovací_.
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
