---
title: Ukázkový skript Azure CLI – Vytvoření transformace | Microsoft Docs
description: Transformace popisují jednoduchý pracovní postup úloh pro zpracování videosouborů nebo zvukových souborů a často se označují jako předpisy. Skript Azure CLI v tomto článku znázorňuje, jak vytvořit transformaci.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: multiple
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/18/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5ffab4eaf63844057a3872730f91634cb2df5669
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2020
ms.locfileid: "94918136"
---
# <a name="create-a-transform"></a>Vytvoření transformace

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Skript Azure CLI v tomto článku znázorňuje, jak vytvořit transformaci. Transformace popisují jednoduchý pracovní postup úloh pro zpracování videosouborů nebo zvukových souborů a často se označují jako předpisy. Vždy byste měli zkontrolovat, zda už neexistuje transformace s požadovaným názvem a předpisem. Pokud existuje, měli byste ji použít.

## <a name="prerequisites"></a>Požadavky

[Vytvořte účet Media Services](./create-account-howto.md).

## <a name="cli"></a>[Rozhraní příkazového řádku](#tab/cli/)

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

> [!NOTE]
> Můžete zadat pouze cestu k vlastnímu souboru JSON přednastavení Standard Encoder pro [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset), viz [kódování s vlastní transformací](custom-preset-cli-howto.md) .
>
> Při použití [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset)nelze předat název souboru.

## <a name="example-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-transform/Create-Transform.sh "Create a transform")]

## <a name="rest"></a>[REST](#tab/rest/)

[!INCLUDE [task general transform creation](./includes/task-create-transform-rest.md)]

---

## <a name="next-steps"></a>Další kroky

[Další informace o transformacích a úlohách](transforms-jobs-concept.md)
