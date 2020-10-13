---
title: Nahrání obsahu do Azure Media Services prostředku pomocí Azure CLI
description: Skript Azure CLI v tomto tématu znázorňuje, jak vytvořit asset služby Azure Media Services, do kterého je možné nahrát obsah.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: aa5c9178bc84983b7e577cd63cf5b8d9fb9a8436
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89254657"
---
# <a name="create-an-asset"></a>Vytvoření prostředku

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Tento článek ukazuje, jak vytvořit prostředek Media Services.  K uchování mediálního obsahu pro kódování a streamování použijete Asset.  Další informace o Media Services assety najdete [v článku Azure Media Services V3](assets-concept.md) .

## <a name="prerequisites"></a>Požadavky

Postupujte podle kroků v části [Vytvoření účtu Media Services](./create-account-howto.md) a vytvořte potřebný Media Services účet a skupinu prostředků pro vytvoření assetu.

## <a name="methods"></a>Metody

## <a name="cli"></a>[Rozhraní příkazového řádku](#tab/cli/)

[!INCLUDE [Create an asset with CLI](./includes/task-create-asset-cli.md)]

## <a name="example-script"></a>Ukázkový skript

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-asset/Create-Asset.sh "Create an asset")]

## <a name="rest"></a>[REST](#tab/rest/)

### <a name="using-rest"></a>S využitím rozhraní REST

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-rest.md)]

### <a name="using-curl"></a>S využitím cURL

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-curl.md)]

## <a name="using-postman"></a>S využitím Postmana

[!INCLUDE[Create an asset with Postman](./includes/task-create-asset-postman.md)]

## <a name="net"></a>[.NET](#tab/net/)

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-dotnet.md)]

---

## <a name="next-steps"></a>Další kroky

[Přehled Media Services](media-services-overview.md)
