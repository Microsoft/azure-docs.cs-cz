---
title: Prostor scény
description: Popisuje nastavení prostoru fáze a případy použití.
author: christophermanthei
ms.author: chmant
ms.date: 03/07/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 4a739907ce0a3b0b6dfcb8791b51d0ea5e7e76e7
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594006"
---
# <a name="stage-space"></a>Prostor scény

Při spuštění funkce ARR na zařízení, které poskytuje data sledování hlav, jako je HoloLens 2, se pozice hlavní pozice odesílá do uživatelské aplikace i do serveru. Místo, ve kterém je transformace Head definována, se nazývá *prostor fáze*.

Pro zarovnávání místního a vzdáleného obsahu se předpokládá, že prostor fáze a světový prostor jsou na klientovi i na serveru stejné. Pokud se uživatel rozhodne přidat další transformaci na kameru, je nutné ji odeslat na server a správně zarovnat místní a vzdálený obsah.

Typickými důvody pro přesun prostoru fáze jsou [Nástroje pro uzamykání na světě](https://microsoft.github.io/MixedReality-WorldLockingTools-Unity/README.html) nebo jiné techniky pro kotvy reálného světa a přesun virtuálního znaku v VR.

## <a name="stage-space-settings"></a>Nastavení prostoru fáze

> [!CAUTION]
> EXPERIMENTÁLNÍ: Tato funkce je experimentální a v průběhu času se změní. Proto aktualizace na novější verzi klientských SDK může vyžadovat další práci k upgradu kódu. Současná implementace při změně počátku místa fáze přeruší zarovnání místních nebo vzdálených obsahu.
Proto je v současné době určena pouze pro účely zamykání světa, jako jsou kotvy, které v průběhu času vykazují pouze velmi malé změny.

Aby bylo možné informovat Server, že se v prostoru fáze aplikuje další transformace, je nutné odeslat jeho původ na pozici a rotaci v prostoru světa. Toto nastavení je dostupné prostřednictvím *Nastavení prostoru fáze*.

> [!IMPORTANT]
> V [simulaci plochy](../../concepts/graphics-bindings.md) je umístění kamery na světě zajištěno uživatelskou aplikací. V takovém případě je nutné nastavit, aby byl počátek prostoru fáze přeskočen, protože již byl vynásobený z převodu kamery.

```cs
void ChangeStageSpace(RenderingSession session)
{
    StageSpaceSettings settings = session.Connection.StageSpaceSettings;

    // Set position and rotation to the world-space transform of the stage space.
    settings.Position = new Double3(0, 0, 0);
    settings.Rotation = new Quaternion(0, 0, 0, 1);
}
```

```cpp
void ChangeStageSpace(ApiHandle<RenderingSession> session)
{
    ApiHandle<StageSpaceSettings> settings = session->Connection()->GetStageSpaceSettings();

    // Set position and rotation to the world-space transform of the stage space.
    settings->SetPosition({0, 0, 0});
    settings->SetRotation({0, 0, 0, 1});
}
```

## <a name="unity-stage-space-script"></a>Skript prostoru fáze Unity

Integrace Unity poskytuje skript s názvem **StageSpace** , který se dá přidat k nadřazenému GameObject kamery. Po zobrazení se tento skript postará o nastavení zdroje místa fáze.

## <a name="next-steps"></a>Další kroky

* [Grafika – vazba](../../concepts/graphics-bindings.md)
* [Reprojekce pozdní fáze](late-stage-reprojection.md)
