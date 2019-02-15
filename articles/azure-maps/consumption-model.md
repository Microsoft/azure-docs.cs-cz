---
title: Využití modelu ve službě Azure Maps | Dokumentace Microsoftu
description: Další informace o spotřebě modelu ve službě Azure Maps
author: subbarayudukamma
ms.author: skamma
ms.date: 05/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 5f75f656312c11a4668ca9ef9fe7b2a61a7d13e8
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56301135"
---
# <a name="consumption-model"></a>Model spotřeby

Online směrování poskytuje podrobný popis modelu využití vozidla konkrétní sadu parametrů.
V závislosti na hodnotě z **vehicleEngineType**, jsou podporovány dvě hlavní spotřební modely: _Spalovací_ a _Electric_. Zadání parametrů, které patří do různých modelů ve stejném požadavku, je chyba.
Využití modelu nelze použít s **travelMode** hodnoty _jízdních kol_ a _chodce_.

## <a name="parameter-constraints-for-consumption-model"></a>Omezení parametru pro model spotřeby

V obou modelech spotřeby explicitní určení některé parametry vyžaduje zadání některých i další uživatele. Tyto závislosti jsou:

* Vyžadovat všechny parametry **constantSpeedConsumption** do zadaného uživatelem. Jedná se o chybu, chcete-li určit všechny ostatní využití modelu parametry, s výjimkou z **vehicleWeight**, pokud **constantSpeedConsumption** není zadán.
* **accelerationEfficiency** a **decelerationEfficiency** vždy třeba zadat jako párový údaj (tj oba, nebo žádný).
* Pokud **accelerationEfficiency** a **decelerationEfficiency** zadávají produktu jejich hodnoty nesmí být větší než 1 (aby se zabránilo perpetual pohybu).
* **uphillEfficiency** a **downhillEfficiency** vždy třeba zadat jako párový údaj (tj oba, nebo žádný).
* Pokud **uphillEfficiency** a **downhillEfficiency** zadávají produktu jejich hodnoty nesmí být větší než 1 (aby se zabránilo perpetual pohybu).
* Pokud \* __efektivitu__ parametry jsou zadané uživatelem, **vehicleWeight** musí být také zadána. Když **vehicleEngineType** je _spalovací_, **fuelEnergyDensityInMJoulesPerLiter** musí být zadán také.
* **maxChargeInkWh** a **currentChargeInkWh** vždy třeba zadat jako párový údaj (tj oba, nebo žádný).

> [!NOTE]
> Pokud pouze **constantSpeedConsumption** není zadán, jsou žádné další aspekty spotřeby svazích a akceleraci vozidla vzít v úvahu pro výpočty spotřeby.

## <a name="combustion-consumption-model"></a>Model spotřeby spalování

Model spotřeby combustion se používá, když je **vehicleEngineType** nastaveno na _combustion_.
Seznam parametrů, které patří do tohoto modelu jsou uvedené níže. Přečtěte si část parametry pro podrobný popis.

* constantSpeedConsumptionInLitersPerHundredkm
* VehicleWeight
* currentFuelInLiters
* auxiliaryPowerInLitersPerHour
* fuelEnergyDensityInMJoulesPerLiter
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="electric-consumption-model"></a>Model spotřeby elektrické

Slouží k elektrické spotřební Model při **vehicleEngineType** je nastavena na _electric_.
Seznam parametrů, které patří do tohoto modelu jsou uvedené níže. Přečtěte si část parametry pro podrobný popis.

* constantSpeedConsumptionInkWhPerHundredkm
* VehicleWeight
* currentChargeInkWh
* maxChargeInkWh
* auxiliaryPowerInkW
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="sensible-values-of-consumption-parameters"></a>Rozumné hodnoty parametrů využití

Může být odmítnutá konkrétní sadu parametrů spotřeby, i když ho může splnit všechny explicitní výše uvedené požadavky na. To se stane, když se bude považovat za hodnotu určitý parametr nebo kombinace hodnot několik parametrů, povede k odpor řádově hodnoty spotřeby. Pokud k tomu dojde, pravděpodobně označuje chybu vstupu jako správné je pozor pojmout všechny hodnoty rozumné využití parametrů. V případě, že konkrétní sadu parametrů spotřeby byl odmítnut, doprovodné chybové zprávě bude obsahovat textové vysvětlení z důvodů.
Podrobný popis parametrů obsahují příklady rozumné hodnoty pro oba modely.
