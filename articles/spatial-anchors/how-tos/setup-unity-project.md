---
title: Instalace prostorových kotev Azure pro Unity
description: Konfigurace projektu Unity pro použití prostorových kotev Azure
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 07/31/2020
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: a23cff8bcef2d62b1e415997ffd6afc80cf47793
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/05/2020
ms.locfileid: "87812288"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>Konfigurace prostorových kotev Azure v projektu Unity

## <a name="requirements"></a>Požadavky

Prostorová ukotvení Azure aktuálně podporuje Unity 2019,4 (LTS) s následujícími konfiguracemi.

* V prostorových ukotveních Azure 2.4.0 + je podporovaná Unity 2019,4 s AR Foundation 3,1.

## <a name="configuring-a-project"></a>Konfigurace projektu

Prostorové kotvy Azure pro Unity se momentálně distribuují pomocí balíčku assetů Unity ( `.unitypackage` ), který najdete ve [vydaných vydáních GitHubu](https://github.com/Azure/azure-spatial-anchors-samples/releases).

### <a name="import-the-asset-package"></a>Import balíčku assetů

1. Stáhněte si `AzureSpatialAnchors.unitypackage` soubor pro verzi, kterou chcete cílit ze [vydání GitHubu](https://github.com/Azure/azure-spatial-anchors-samples/releases).
2. Postupujte podle pokynů uvedených [tady](https://docs.unity3d.com/Manual/AssetPackagesImport.html) a importujte balíček Asset Unity do svého projektu.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Postupy: vytváření a hledání kotev v Unity](./create-locate-anchors-unity.md)
