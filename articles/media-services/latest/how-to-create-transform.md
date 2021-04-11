---
title: Ukázkový skript Azure CLI – vytvoření transformace
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
ms.openlocfilehash: 37e2dc194eb2ebbcbe91eef6e50f3eb1e7b62e17
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106110389"
---
# <a name="create-a-transform"></a>Vytvoření transformace

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Skript Azure CLI v tomto článku znázorňuje, jak vytvořit transformaci. Transformace popisují jednoduchý pracovní postup úloh pro zpracování videosouborů nebo zvukových souborů a často se označují jako předpisy. Vždy byste měli zkontrolovat, zda už neexistuje transformace s požadovaným názvem a předpisem. Pokud existuje, měli byste ji použít.

## <a name="prerequisites"></a>Požadavky

[Vytvořte účet Media Services](./account-create-how-to.md).

## <a name="cli"></a>[Rozhraní příkazového řádku](#tab/cli/)

> [!NOTE]
> Můžete zadat pouze cestu k vlastnímu souboru JSON přednastavení Standard Encoder pro [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset), viz [kódování s vlastní transformací](encode-custom-preset-cli-how-to.md) .
>
> Při použití [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset)nelze předat název souboru.

## <a name="example-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-transform/Create-Transform.sh "Create a transform")]

## <a name="rest"></a>[REST](#tab/rest/)

[!INCLUDE [task general transform creation](./includes/task-create-basic-audio-rest.md)]

---

## <a name="next-steps"></a>Další kroky

[!INCLUDE [transforms next steps](./includes/transforms-next-steps.md)]
