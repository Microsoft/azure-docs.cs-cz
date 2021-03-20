---
title: Vytvoření účtu Azure Media Services
description: Tento kurz vás provede jednotlivými kroky při vytváření účtu Azure Media Services.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/4/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: b9234b27e2f08e65f569393bde342cba3f37adee
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95971347"
---
# <a name="create-a-media-services-account"></a>Vytvoření účtu Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Pokud chcete začít šifrovat, kódovat, analyzovat, spravovat a streamovat mediální obsah v Azure, musíte vytvořit účet Media Services. Účet Media Services musí být přidružený k jednomu nebo více účtům úložiště. Tento článek popisuje kroky pro vytvoření nového účtu Azure Media Services.

[!INCLUDE [note 2020-05-01 API](./includes/note-2020-05-01-account-creation.md)]

 K vytvoření účtu Media Services můžete použít buď Azure Portal, nebo rozhraní příkazového řádku. Vyberte kartu pro metodu, kterou chcete použít.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

<!-- NOTE: The following are in the includes folder and are reused in other How To articles. All task based content should be in the includes folder with the task- prefix prepended to the file name. -->

## <a name="portal"></a>[Azure Portal](#tab/portal/)

[!INCLUDE [the media services account and storage account must be in the same subscription](./includes/note-account-storage-same-subscription.md)]

[!INCLUDE [create a media services account in the portal](./includes/task-create-media-services-account-portal.md)]

## <a name="cli"></a>[Rozhraní příkazového řádku](#tab/cli/)

[!INCLUDE [the media services account and storage account must be in the same subscription](./includes/note-account-storage-same-subscription.md)]

## <a name="set-the-azure-subscription"></a>Nastavení předplatného Azure

[!INCLUDE [Set the Azure subscription with CLI](./includes/task-set-azure-subscription-cli.md)]

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

[!INCLUDE [Create a resource group with CLI](./includes/task-create-resource-group-cli.md)]

## <a name="create-a-storage-account"></a>Vytvoření účtu úložiště

[!INCLUDE [Create a storage account with CLI](./includes/task-create-storage-account-cli.md)]

## <a name="create-a-media-services-account"></a>Vytvoření účtu Media Services

[!INCLUDE [Create a Media Services account with CLI](./includes/task-create-media-services-account-cli.md)]

---

## <a name="next-steps"></a>Další kroky

[Streamování souboru](stream-files-dotnet-quickstart.md)