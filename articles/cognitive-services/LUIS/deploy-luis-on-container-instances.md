---
title: Nasazení kontejneru LUIS ve službě Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Nasaďte kontejner LUIS do instance kontejneru Azure a otestujte ho ve webovém prohlížeči.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: dapine
ms.openlocfilehash: 30fd19634f6054b8b636dabcb4ef83b118554468
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2020
ms.locfileid: "75689444"
---
# <a name="deploy-the-language-understanding-luis-container-to-azure-container-instances"></a>Nasazení kontejneru Language Understanding (LUIS) do služby Azure Container Instances

Naučte se, jak nasadit kontejner Cognitive Services [Luis](luis-container-howto.md) do služby Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/). Tento postup ukazuje vytvoření prostředku detektoru anomálií. Pak se podíváme na navýšení přidružené image kontejneru. Nakonec zvýrazníme možnost cvičení těchto dvou z prohlížeče. Pomocí kontejnerů můžete před správou infrastruktury místo toho, aby se zaměřily na vývoj aplikací, posunout pozornost vývojářů.

[!INCLUDE [Prerequisites](../containers/includes/container-prerequisites.md)]

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

## <a name="create-an-azure-file-share"></a>Vytvoření sdílené složky Azure

Kontejner LUIS vyžaduje soubor modelu `.gz`, který se během běhu vyžádal. Kontejner musí být schopný získat přístup k tomuto souboru modelu prostřednictvím připojení svazku z instance kontejneru. Informace o tom, jak vytvořit sdílenou složku Azure, najdete v tématu [Vytvoření sdílení souborů](../../storage/files/storage-how-to-create-file-share.md). Poznamenejte si název účtu Azure Storage, klíč a název sdílené složky, abyste je mohli později potřebovat.

### <a name="export-and-upload-packaged-luis-app"></a>Export a nahrání zabalených aplikací v LUIS

Aby bylo možné nahrát LUIS model (zabalenou aplikaci) do sdílené složky Azure, budete <a href="luis-container-howto.md#export-packaged-app-from-luis" target="_blank" rel="noopener">ho muset nejdřív <span class="docon docon-navigate-external x-hidden-focus"> </span>exportovat z portálu Luis </a>. V Azure Portal přejděte na stránku **Přehled** prostředku účtu úložiště a vyberte **sdílené složky**. Vyberte název sdílené složky, který jste nedávno vytvořili, a pak vyberte tlačítko **nahrát** .

> [!div class="mx-imgBorder"]
> ![odeslat do sdílené složky](media/luis-how-to-deploy-to-aci/upload-file-share.png)

Nahrajte soubor modelu LUIS.

[!INCLUDE [Create LUIS Container instance resource](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Next steps](../containers/includes/containers-next-steps.md)]
