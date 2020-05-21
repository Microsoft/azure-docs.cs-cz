---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/2/2019
ms.author: crtreasu
ms.openlocfilehash: e3ef7e7b8f527cde1fcfbb13141fd3a520921267
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83673420"
---
Dalším krokem je konfigurace aplikace tak, aby používala identifikátor účtu a klíč účtu. Při [nastavování prostředku prostorových ukotvení](#create-a-spatial-anchors-resource)jste je zkopírovali do textového editoru.

V podokně **projekt** přejděte na `Assets\AzureSpatialAnchors.SDK\Resources` . Vyberte `SpatialAnchorConfig`. Poté v podokně **inspektor** zadejte `Account Key` jako hodnotu pro `Spatial Anchors Account Key` a `Account ID` jako hodnotu pro `Spatial Anchors Account Id` .

Pak otevřete `SpatialAnchorManager.cs` . Vyhledejte `CreateSessionAsync()` a přidejte následující řádek, ve kterém nahradíte doménu účtu v minulosti: `session.Configuration.AccountId = "MyAccountDomain";` . Tento řádek můžete přidat přímo před tento komentář `// Configure authentication` .
