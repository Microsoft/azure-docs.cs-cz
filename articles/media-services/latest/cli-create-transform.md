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
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: dd2efc820bd2f35dee8e27737e7de919e6af2f30
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/01/2020
ms.locfileid: "89269773"
---
# <a name="cli-example-create-a-transform"></a>Ukázka CLI: Vytvoření transformace

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Skript Azure CLI v tomto článku znázorňuje, jak vytvořit transformaci. Transformace popisují jednoduchý pracovní postup úloh pro zpracování videosouborů nebo zvukových souborů a často se označují jako předpisy. Vždy byste měli zkontrolovat, zda už neexistuje transformace s požadovaným názvem a předpisem. Pokud existuje, měli byste ji použít.

## <a name="prerequisites"></a>Požadavky 

[Vytvořte účet Media Services](./create-account-howto.md).

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

> [!NOTE]
> Můžete zadat pouze cestu k vlastnímu souboru JSON přednastavení Standard Encoder pro [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset), viz [kódování s vlastní transformací](custom-preset-cli-howto.md) .
>
> Při použití [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset)nelze předat název souboru.

## <a name="example-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-transform/Create-Transform.sh "Create a transform")]

## <a name="next-steps"></a>Další kroky

[AZ AMS Transform (CLI)](/cli/azure/ams/transform?view=azure-cli-latest)
