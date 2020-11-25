---
title: Modely spotřeby vozidel pro směrování | Mapy Microsoft Azure
description: 'Přečtěte si o modelech spotřeby, které Azure Maps podporuje: spalování a elektroinstalace. Podívejte se, které parametry používá každý model a jak zobrazit omezení parametrů.'
author: subbarayudukamma
ms.author: skamma
ms.date: 05/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: b44186d783a249192a8c13ee97063034ee319df7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013135"
---
# <a name="consumption-model"></a>Model spotřeby

Směrovací služba poskytuje sadu parametrů pro podrobný popis modelu spotřeby specifického pro vozidlo.
V závislosti na hodnotě **vehicleEngineType** jsou podporovány dva základní modely spotřeby: _spalování_ a _elektroinstalace_. Je nesprávné zadat parametry, které patří do různých modelů ve stejné žádosti. Parametry modelu spotřeby se navíc nedají použít s následujícími **travelMode** hodnotami: _jízdní kolo_ a _pěší_.

## <a name="parameter-constraints-for-consumption-model"></a>Omezení parametrů pro model spotřeby

V obou modelech spotřeby existují při zadávání parametrů některé závislosti. To znamená, že explicitní určení některých parametrů může vyžadovat určení některých dalších parametrů. Tady jsou tyto závislosti, které je potřeba mít na paměti:

* Všechny parametry vyžadují, aby **constantSpeedConsumption** zadal uživatel. Pokud není zadán parametr **constantSpeedConsumption** , je při určení jakéhokoli parametru modelu spotřeby chyba. Parametr **vehicleWeight** je pro tento požadavek výjimkou.
* **accelerationEfficiency** a **decelerationEfficiency** musí být vždy zadány jako dvojice (tj. obojí nebo None).
* Pokud jsou zadány hodnoty **accelerationEfficiency** a **decelerationEfficiency** , nesmí být produkt jejich hodnot větší než 1 (aby nedocházelo k trvalému pohybu).
* **uphillEfficiency** a **downhillEfficiency** musí být vždy zadány jako dvojice (tj. nebo žádné).
* Pokud jsou zadány hodnoty **uphillEfficiency** a **downhillEfficiency** , nesmí být produkt jejich hodnot větší než 1 (aby nedocházelo k trvalému pohybu).
* Pokud \* uživatel zadá parametry __efektivity__ , musí se také zadat **vehicleWeight** . _Je-li_ **vehicleEngineType** , musí být zadán také parametr **fuelEnergyDensityInMJoulesPerLiter** .
* **maxChargeInkWh** a **currentChargeInkWh** musí být vždy zadány jako dvojice (tj. obojí nebo None).

> [!NOTE]
> Pokud je zadaná jenom **constantSpeedConsumption** , neberou se v úvahu žádné další aspekty využití, jako jsou sklony a akcelerace vozidel pro výpočty spotřeby.

## <a name="combustion-consumption-model"></a>Model spotřeby spalování

Model spotřeby spalování se používá v případě, že je **vehicleEngineType** nastavené na _spalování_.
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

Konkrétní sadu parametrů spotřeby lze odmítnout, i když sada může splnit všechny explicitní požadavky. K tomu dochází, pokud hodnota konkrétního parametru nebo kombinace hodnot několika parametrů je považována za to, že vede k neodůvodněným velikostem hodnot spotřeby. Pokud k tomu dojde, pravděpodobně to indikuje vstupní chybu, protože se postará o to, aby se všechny hodnoty rozumné parametrů spotřeby vešly. V případě zamítnutí konkrétní sady parametrů spotřeby bude příslušná chybová zpráva obsahovat textové vysvětlení důvodů.
Podrobný popis parametrů obsahuje příklady hodnot rozumné pro oba modely.
