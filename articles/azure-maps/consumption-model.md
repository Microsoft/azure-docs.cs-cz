---
title: Modely spotřeby vozidel pro směrování | Mapy Microsoft Azure
description: V tomto článku se dozvíte o modelech spotřeby vozidel pro směrování v Microsoft Azurech mapách.
author: subbarayudukamma
ms.author: skamma
ms.date: 05/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 5a8a0778ce279846b0d7a66b1729b6898e80a4b5
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911711"
---
# <a name="consumption-model"></a>Model spotřeby

Online směrování poskytuje sadu parametrů pro podrobný popis modelu spotřeby specifického pro vozidlo.
V závislosti na hodnotě **vehicleEngineType** jsou podporovány dva hlavní modely spotřeby: _Combustion_ a _Electric_. Zadání parametrů, které patří do různých modelů ve stejném požadavku, je chyba.
Model spotřeby se nedá použít s hodnotami travelMode _bicykl_ a _pěší_.

## <a name="parameter-constraints-for-consumption-model"></a>Omezení parametrů pro model spotřeby

V obou modelech spotřeby vyžaduje explicitní určení některých parametrů také další. Tyto závislosti jsou:

* Všechny parametry vyžadují, aby **constantSpeedConsumption** zadal uživatel. Je-li zadána jakákoli jiná parametr modelu spotřeby s výjimkou **vehicleWeight**, není-li parametr **constantSpeedConsumption** zadán, je chyba.
* **accelerationEfficiency** a **decelerationEfficiency** musí být vždy zadány jako dvojice (tj. obojí nebo None).
* Pokud jsou zadány hodnoty **accelerationEfficiency** a **decelerationEfficiency** , nesmí být produkt jejich hodnot větší než 1 (aby nedocházelo k trvalému pohybu).
* **uphillEfficiency** a **downhillEfficiency** musí být vždy zadány jako dvojice (tj. obojí nebo None).
* Pokud jsou zadány hodnoty **uphillEfficiency** a **downhillEfficiency** , nesmí být produkt jejich hodnot větší než 1 (aby nedocházelo k trvalému pohybu).
* Pokud uživatel zadal parametry __účinnosti__ \*, musí být také zadán parametr **vehicleWeight** . _Je-li_ **vehicleEngineType** , musí být zadán také parametr **fuelEnergyDensityInMJoulesPerLiter** .
* **maxChargeInkWh** a **currentChargeInkWh** musí být vždy zadány jako dvojice (tj. obojí nebo None).

> [!NOTE]
> Pokud je zadaná jenom **constantSpeedConsumption** , neberou se v úvahu žádné další aspekty využití, jako jsou sklony a akcelerace vozidel pro výpočty spotřeby.

## <a name="combustion-consumption-model"></a>Model spotřeby spalování

Model spotřeby combustion se používá, když je **vehicleEngineType** nastaveno na _combustion_.
Níže je uveden seznam parametrů, které patří do tohoto modelu. Podrobný popis najdete v části Parameters.

* constantSpeedConsumptionInLitersPerHundredkm
* vehicleWeight
* currentFuelInLiters
* auxiliaryPowerInLitersPerHour
* fuelEnergyDensityInMJoulesPerLiter
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="electric-consumption-model"></a>Model elektrické spotřeby

Model elektrické spotřeby se používá, když je **vehicleEngineType** nastavený na _elektrické_.
Níže je uveden seznam parametrů, které patří do tohoto modelu. Podrobný popis najdete v části Parameters.

* constantSpeedConsumptionInkWhPerHundredkm
* vehicleWeight
* currentChargeInkWh
* maxChargeInkWh
* auxiliaryPowerInkW
* accelerationEfficiency
* decelerationEfficiency
* uphillEfficiency
* downhillEfficiency

## <a name="sensible-values-of-consumption-parameters"></a>Rozumné hodnoty parametrů spotřeby

Konkrétní sadu parametrů spotřeby lze odmítnout, i když může splňovat všechny výslovně uvedené požadavky. K tomu dochází, pokud hodnota konkrétního parametru nebo kombinace hodnot několika parametrů je považována za to, že vede k neodůvodněným velikostem hodnot spotřeby. Pokud k tomu dojde, pravděpodobně to indikuje vstupní chybu, protože se postará o to, aby se všechny hodnoty rozumné parametrů spotřeby vešly. V případě zamítnutí konkrétní sady parametrů spotřeby bude příslušná chybová zpráva obsahovat textové vysvětlení důvodů.
Podrobný popis parametrů obsahuje příklady hodnot rozumné pro oba modely.
