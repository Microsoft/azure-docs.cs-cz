---
title: Systémy souřadnic Azure Kinect DK
description: Popis souřadnicových systémů Azure Kinect DK přidružených k senzorům Azure DK
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, snímač, SDK, hloubka kamery, TOF, zásady, výkon, neplatnost
ms.openlocfilehash: 4bb1c3b79862b918870cff786042d9b4c66270d1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "85277136"
---
# <a name="azure-kinect-dk-coordinate-systems"></a>Systémy souřadnic Azure Kinect DK

V tomto článku popisujeme konvence používané pro 2D a 3D systémy souřadnic.  Existují samostatné systémy souřadnic, které jsou přidružené k zařízení každého snímače, a [funkce kalibrace](use-calibration-functions.md) povolené k transformaci bodů mezi nimi. [Transformační funkce](use-image-transformation.md) transformují celé obrázky mezi systémy souřadnic.  

## <a name="2d-coordinate-systems"></a>Dvojrozměrné systémy souřadnic

 K nezávislému 2D souřadnicovým systémům jsou přidruženy hloubkové a barevné kamery. Souřadnice [x, y] je reprezentovaná v jednotkách v pixelech, kde *x* rozsahy od 0 do šířky-1 a *y* rozsahu od 0 do výšky 1. Šířka a výška závisí na zvoleném režimu, ve kterém jsou ovládány hloubkové a barevné kamery. Souřadnice pixelu `[0,0]` odpovídá levému hornímu obrazovému bodu obrázku. Souřadnice pixelů mohou být zlomky reprezentující souřadnice podpixelu.

2D souřadnicový systém je 0 – Centrované, tj. souřadnice dolního pixelu `[0.0, 0.0]` představuje střed a `[0.5,0.5]` pravý dolní roh pixelu, jak je znázorněno níže.

   ![2D systém souřadnic](./media/concepts/concepts-coordinate-systems/coordinate-systems-sdk-2d-system.png)

## <a name="3d-coordinate-systems"></a>3D systémy souřadnic

Každý fotoaparát, akcelerometr a vybavený gyroskopem jsou přidruženy k nezávislému systému prostorové souřadnice.

Body v systémech 3D souřadnic jsou reprezentovány jako metrika [X, Y, Z] – koordinuje Triplets s jednotkami v milimetrech.

### <a name="depth-and-color-camera"></a>Hloubka a barevná kamera

Počátek `[0,0,0]` je umístěný na kontaktním místě kamery. Systém souřadnic je orientovaný na to, že kladné body osy X, kladné body osy Y a kladné body Z osy Z.

Hloubka kamery je nakloněna o 6 stupňů dolů barevné kamery, jak je znázorněno níže. 

Existují dva osvětlení používané hloubkovou kamerou. Osvětlovací modul, který se používá v úzkých režimech NFOV (s úzkým zobrazením), je zarovnán s hloubkovým případem kamery, takže se nenakloněný. Osvětlovací modul, který se používá v různých režimech zobrazení (WFOV), je nakloněný o dalších 1,3 stupňů směrem dolů vzhledem k hloubce kamery.

![3D konvence souřadnic](./media/concepts/concepts-coordinate-systems/coordinate-systems-camera-features.png)

### <a name="gyroscope-and-accelerometer"></a>Vybavený gyroskopem a akcelerometr

Původ vybavený gyroskopem `[0,0,0]` je totožný s počátkem hloubkové kamery. Počátek akcelerometru se shoduje s jeho fyzickou polohou. Systémy souřadnic pro akcelerometr a vybavený gyroskopem jsou pravé. Pozitivní body osy X souřadnicového systému jsou kladné body osy Y vlevo a kladné body Z osy Z, jak je znázorněno níže.

![Systém souřadnic IMU](./media/concepts/concepts-coordinate-systems/coordinate-systems-gyroscope.png)

## <a name="next-steps"></a>Další kroky

[Seznamte se s Azure Kinect snímače SDK](about-sensor-sdk.md)