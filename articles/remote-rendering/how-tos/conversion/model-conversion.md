---
title: Převod modelu
description: Popisuje proces převodu modelu pro vykreslování.
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: e899b249261ea3238695a2e2be6001cb6a9bc763
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91318054"
---
# <a name="convert-models"></a>Převod modelů

Vzdálené vykreslování Azure umožňuje vykreslit velmi složité modely. Aby bylo dosaženo maximálního výkonu, musí být data předzpracovaná v optimálním formátu. V závislosti na množství dat může tento krok chvíli trvat. Je-li tento čas stráven během načítání modelu, je nepraktické. Také by se wasteful opakovat tento postup pro více relací. Z těchto důvodů služba ARR poskytuje vyhrazenou *službu převodu*, která může běžet předem.
Po převedení se model dá načíst z Azure Storage účtu.

## <a name="supported-source-formats"></a>Podporované formáty zdroje

Služba konverze podporuje tyto formáty:

- **FBX**  (verze 2011 až verze 2020)
- **GLTF** / **GLB** (verze 2. x)

Existují drobné rozdíly mezi formáty s ohledem na převod vlastností materiálu, jak je uvedeno v kapitole [mapování materiálů pro formáty modelů](../../reference/material-mapping.md).

## <a name="the-conversion-process"></a>Proces převodu

1. [Příprava dvou BLOB Storage kontejnerů Azure](blob-storage.md): jedna pro vstup, jedna pro výstup
1. Nahrání modelu do vstupního kontejneru (volitelně pod dílčí cestou)
1. Aktivovat proces převodu pomocí [REST API převodu modelu](conversion-rest-api.md)
1. Dotazování služby na průběh převodu
1. Po dokončení načtěte model.
    - z propojeného účtu úložiště (viz postup propojení účtů úložiště v tématu [Vytvoření účtu](../create-an-account.md#link-storage-accounts) , který propojí váš účet úložiště)
    - nebo poskytnutím *sdíleného přístupového podpisu (SAS)*.

Data všech modelů (vstupní a výstupní) se ukládají do úložiště objektů BLOB v Azure, které zadal uživatel. Vzdálené vykreslování Azure poskytuje plnou kontrolu nad správou prostředků.

## <a name="pricing"></a>Ceny

Informace o cenách pro převod najdete na stránce s [cenami pro vzdálené vykreslování](https://azure.microsoft.com/pricing/details/remote-rendering) .


## <a name="conversion-parameters"></a>Parametry převodu

Různé možnosti převodu naleznete v [této kapitole](configure-model-conversion.md).

## <a name="examples"></a>Příklady

- [Rychlý Start: převod modelu pro vykreslování](../../quickstarts/convert-model.md) je krok za krokem Úvod k převodu modelu.
- [Ukázkové skripty PowerShellu](../../samples/powershell-example-scripts.md), které demonstrují použití služby pro převod, najdete v [úložišti vzorků ARR](https://github.com/Azure/azure-remote-rendering) ve složce *Scripts* .

## <a name="next-steps"></a>Další kroky

- [Použití služby Azure Blob Storage pro převod modelů](blob-storage.md)
- [REST API převodu modelu](conversion-rest-api.md)
- [Konfigurace převodu modelu](configure-model-conversion.md)
- [Příprava souborů pro převod](layout-files-for-conversion.md)
- [Mapování materiálu pro formáty modelů](../../reference/material-mapping.md)
