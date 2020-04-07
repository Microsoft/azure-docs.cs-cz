---
title: Převod modelu
description: Popisuje proces převodu modelu pro vykreslování.
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 135c58a665779ffaad8750ffe618bdbe38639b66
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681490"
---
# <a name="convert-models"></a>Převod modelů

Azure Remote Rendering umožňuje vykreslovat velmi složité modely. Chcete-li dosáhnout maximálního výkonu, musí být data předem zpracována, aby byla v optimálním formátu. V závislosti na množství dat může tento krok chvíli trvat. Bylo by nepraktické, kdyby tento čas strávil během načítání modelu. Také by bylo nehospodárné opakovat tento proces pro více relací. Z těchto důvodů poskytuje služba ARR vyhrazenou *službu převodu*, kterou můžete spustit s předstihem.
Po převodu se model dá načíst z účtu úložiště Azure.

## <a name="supported-source-formats"></a>Podporované zdrojové formáty

Služba převodu podporuje tyto formáty:

- **FBX** (verze 2011 a vyšší)
- **GLTF** (verze 2.x)
- **GLB** (verze 2.x)

Existují menší rozdíly mezi formáty s ohledem na převod vlastností materiálu, jak je uvedeno v mapování materiálu kapitoly [pro formáty modelů](../../reference/material-mapping.md).

## <a name="the-conversion-process"></a>Proces převodu

1. [Příprava dvou kontejnerů úložiště objektů blob Azure:](blob-storage.md)jeden pro vstup, jeden pro výstup
1. Nahrání modelu do vstupního kontejneru (volitelně pod podpoložkou)
1. Spuštění procesu [převodu](conversion-rest-api.md) prostřednictvím rozhraní REST API převodu modelu
1. Dotazování služby na průběh převodu
1. Po dokončení načtěte model
    - z propojeného účtu úložiště (postup "Propojit účty úložiště" na [jdete vytvořit účet](../create-an-account.md#link-storage-accounts) pro propojení účtu úložiště)
    - nebo poskytnutím *sdíleného přístupového podpisu (SAS).*

Všechna data modelu (vstup a výstup) se ukládají v uživatelském úložišti objektů blob Azure. Vzdálené vykreslování Azure vám dává plnou kontrolu nad správou vašich prostředků.

## <a name="conversion-parameters"></a>Parametry převodu

Různé možnosti převodu naleznete v [této kapitole](configure-model-conversion.md).

## <a name="examples"></a>Příklady

- [Úvodní příručka: Převod modelu pro vykreslování](../../quickstarts/convert-model.md) je podrobný úvod, jak převést model.
- [Příklad skripty prostředí PowerShell](../../samples/powershell-example-scripts.md), které ukazují použití služby převodu, lze nalézt v [úložišti vzorků ARR](https://github.com/Azure/azure-remote-rendering) ve složce *Skripty.*

## <a name="next-steps"></a>Další kroky

- [Použití azure blob storage pro převod modelu](blob-storage.md)
- [Rozhraní REST API převodu modelu](conversion-rest-api.md)
- [Konfigurace převodu modelu](configure-model-conversion.md)
- [Mapování materiálu pro formáty modelů](../../reference/material-mapping.md)
