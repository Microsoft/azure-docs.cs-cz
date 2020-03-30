---
title: Modely spotřeby vozidel pro směrování | Mapy Microsoft Azure
description: V tomto článku se dozvíte o modelech spotřeby vozidel pro směrování v Mapách Microsoft Azure.
author: subbarayudukamma
ms.author: skamma
ms.date: 05/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: c1572eddf78ca2d5f8f4e3ee9f1fe47b0d43f5aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190247"
---
# <a name="consumption-model"></a>Model spotřeby

Služba Směrování poskytuje sadu parametrů pro podrobný popis modelu spotřeby specifického pro vozidlo.
V závislosti na hodnotě **vehicleEngineType**jsou podporovány dva hlavní modely spotřeby: _Spalování_ a _Elektrické_. Je nesprávné zadat parametry, které patří do různých modelů ve stejném požadavku. Parametry modelu spotřeby nelze také použít s následujícími hodnotami **travelMode:** _bicycle_ a _pedestrian_.

## <a name="parameter-constraints-for-consumption-model"></a>Omezení parametrů pro model spotřeby

V obou modelech spotřeby existují některé závislosti při zadávání parametrů. To znamená, že explicitní zadání některých parametrů může vyžadovat zadání některých dalších parametrů. Zde jsou tyto závislosti, které je třeba znát:

* Všechny parametry vyžadují **constantSpeedConsumption,** které mají být určeny uživatelem. Je chyba určit jakýkoli jiný parametr modelu spotřeby, pokud není zadán a zadán **constantSpeedConsumption.** Parametr **VehicleWeight** je výjimkou pro tento požadavek.
* **accelerationEfficiency** a **zpomaleníÚčinnost** musí být vždy zadán jako pár (to znamená, že obojí nebo žádný).
* Pokud **accelerationEfficiency** a **zpomaleníÚčinnost** jsou zadány, součin jejich hodnoty nesmí být větší než 1 (aby se zabránilo trvalý pohyb).
* **efektivita a** **sjezdEfektivita** musí být vždy specifikována jako pár (to je obojí nebo žádný).
* Pokud **do kopceÚčinnost** a **downhillEfficiency** jsou specifikovány, součin jejich hodnoty nesmí být větší než 1 (aby se zabránilo trvalý pohyb).
* \*Pokud jsou parametry __účinnosti__ určeny uživatelem, musí být zadána také **hmotnost vozidla.** Když **vehicleEngineType** je _spalování_, **fuelEnergyDensityInMJoulesPerLiter** musí být specifikována také.
* **maxChargeInkWh** a **currentChargeInkWh** musí být vždy zadány jako pár (to znamená obojí nebo žádné).

> [!NOTE]
> Pokud je zadána pouze **constantSpeedConsumption,** žádné jiné aspekty spotřeby, jako jsou svahy a zrychlení vozidla jsou brány v úvahu pro výpočty spotřeby.

## <a name="combustion-consumption-model"></a>Model spotřeby spalování

Model spotřeby spalování se používá, když **vehicleEngineType** je nastavena na _spalování_.
Seznam parametrů, které patří do tohoto modelu jsou níže. Podrobný popis naleznete v části Parametry.

* constantSpeedConsumptionInLitresPerHundredkm
* vozidloHmotnost
* currentFuelInLitres
* pomocnéPowerInLitersPerHour
* palivoEnergyDensityInMJoulesPerLiter
* akceleraceÚčinnost
* zpomaleníÚčinnost
* efektivita do kopce
* sjezdové Účinnost

## <a name="electric-consumption-model"></a>Model spotřeby elektrické energie

Model spotřeby elektrické energie se používá, když **vehicleEngineType** je nastavena na _elektrickou_.
Seznam parametrů, které patří do tohoto modelu jsou níže. Podrobný popis naleznete v části Parametry.

* constantSpeedConsumptionInkWhPerHundredkm
* vozidloHmotnost
* currentChargeInkWh
* maxChargeInkWh
* pomocnýPowerInkW
* akceleraceÚčinnost
* zpomaleníÚčinnost
* efektivita do kopce
* sjezdové Účinnost

## <a name="sensible-values-of-consumption-parameters"></a>Rozumné hodnoty parametrů spotřeby

Určitou sadu parametrů spotřeby lze odmítnout, i když sada může splňovat všechny explicitní požadavky. Stává se, když hodnota konkrétního parametru nebo kombinace hodnot několika parametrů vede k nepřiměřeným veličinám hodnot spotřeby. Pokud k tomu dojde, s největší pravděpodobností označuje vstupní chybu, protože je věnována náležitá péče, aby se přizpůsobily všem rozumným hodnotám parametrů spotřeby. V případě, že je odmítnuta určitá sada parametrů spotřeby, doprovodná chybová zpráva bude obsahovat textové vysvětlení důvodu (důvodů).
Podrobný popis parametrů obsahuje příklady rozumných hodnot pro oba modely.
