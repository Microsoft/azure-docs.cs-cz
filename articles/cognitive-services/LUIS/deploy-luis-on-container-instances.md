---
title: Nasazení kontejneru LUIS v instanci azure kontejnerů
titleSuffix: Azure Cognitive Services
description: Nasaďte kontejner LUIS do instance kontejneru Azure a otestujte ho ve webovém prohlížeči.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: aahi
ms.openlocfilehash: 08af17106846a0f5f7a0ccc2b01da1b2e15c1143
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879194"
---
# <a name="deploy-the-language-understanding-luis-container-to-azure-container-instances"></a>Nasazení kontejneru language understanding (LUIS) do instancí kontejneru Azure

Zjistěte, jak nasadit kontejner [LUIS](luis-container-howto.md) služeb cognitive services do [instancí Azure Container](https://docs.microsoft.com/azure/container-instances/). Tento postup ukazuje vytvoření prostředku detektoru anomálií. Pak budeme diskutovat o vytažení přidružené image kontejneru. Nakonec zvýrazněme schopnost vykonávat orchestraci obou z prohlížeče. Pomocí kontejnerů můžete přesunout pozornost vývojářů od správy infrastruktury místo toho se zaměřením na vývoj aplikací.

[!INCLUDE [Prerequisites](../containers/includes/container-prerequisites.md)]

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="create-an-azure-file-share"></a>Vytvoření sdílené složky Azure

Kontejner LUIS vyžaduje `.gz` soubor modelu, který je vyžádán za běhu. Kontejner musí mít přístup k tomuto souboru modelu prostřednictvím připojení svazku z instance Container. Informace o vytvoření sdílené složky Azure najdete [v tématu vytvoření sdílené složky](../../storage/files/storage-how-to-create-file-share.md). Název účtu Azure Storage, klíč a název sdílené složky Azure Storage si všimněte, protože je budete později potřebovat.

### <a name="export-and-upload-packaged-luis-app"></a>Export a nahrávání zabalené aplikace LUIS

Chcete-li nahrát model LUIS (zabalenou aplikaci) do sdílené složky Azure, budete ho muset <a href="luis-container-howto.md#export-packaged-app-from-luis" target="_blank" rel="noopener">nejprve <span class="docon docon-navigate-external x-hidden-focus"> </span>exportovat z portálu LUIS </a>. Na webu Azure najdete na stránce **Přehled** prostředku účtu úložiště a vyberte **Sdílené položky souborů**. Vyberte název sdílené složky, který jste nedávno vytvořili, a pak vyberte tlačítko **Odeslat.**

> [!div class="mx-imgBorder"]
> ![Nahrát do sdílené složky](media/luis-how-to-deploy-to-aci/upload-file-share.png)

Nahrajte soubor modelu LUIS.

[!INCLUDE [Create LUIS Container instance resource](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Next steps](../containers/includes/containers-next-steps.md)]
