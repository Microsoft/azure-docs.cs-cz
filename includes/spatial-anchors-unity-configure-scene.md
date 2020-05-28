---
author: craigktreasure
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/2/2019
ms.author: crtreasu
ms.openlocfilehash: 7e7825e8247e78cbc0c0e9e22bdbd9326939e0a8
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "83998039"
---
Dalším krokem je konfigurace aplikace tak, aby používala identifikátor účtu a klíč účtu. Při [nastavování prostředku prostorových ukotvení](#create-a-spatial-anchors-resource)jste je zkopírovali do textového editoru.

V podokně **projekt** přejděte na `Assets\AzureSpatialAnchors.SDK\Resources` . Vyberte `SpatialAnchorConfig`. Poté v podokně **inspektor** zadejte `Account Key` jako hodnotu pro `Spatial Anchors Account Key` a `Account ID` jako hodnotu pro `Spatial Anchors Account Id` .

Pak otevřete `SpatialAnchorManager.cs` . Vyhledejte `CreateSessionAsync()` a přidejte následující řádek, ve kterém nahradíte doménu účtu v minulosti: `session.Configuration.AccountDomain = "MyAccountDomain";` . Tento řádek můžete přidat přímo před tento komentář `// Configure authentication` .
