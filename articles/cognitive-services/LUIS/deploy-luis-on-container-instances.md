---
title: Nasazení kontejneru LUIS v instanci azure kontejnerů
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "75689444"
---
# <a name="deploy-the-language-understanding-luis-container-to-azure-container-instances"></a>Nasazení kontejneru language understanding (LUIS) do instancí kontejneru Azure

Zjistěte, jak nasadit kontejner [LUIS](luis-container-howto.md) služeb cognitive services do [instancí Azure Container](https://docs.microsoft.com/azure/container-instances/). Tento postup ukazuje vytvoření prostředku detektoru anomálií. Pak budeme diskutovat o vytažení přidružené image kontejneru. Nakonec zvýrazněme schopnost vykonávat orchestraci obou z prohlížeče. Pomocí kontejnerů můžete přesunout pozornost vývojářů od správy infrastruktury místo toho se zaměřením na vývoj aplikací.

[!INCLUDE [Prerequisites](../containers/includes/container-prerequisites.md)]

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

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
